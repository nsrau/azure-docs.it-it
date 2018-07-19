---
title: Creare un account delle API Servizi cognitivi nel Portale di Azure | Microsoft Docs
description: Come creare un account delle API Servizi cognitivi Microsoft nel Portale di Azure.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036154"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Creare un account delle API Servizi cognitivi nel portale di Azure

Per usare le API Servizi cognitivi Microsoft, è innanzitutto necessario creare un account nel Portale di Azure.

1. Accedere al [portale di Azure](http://portal.azure.com).

2. Selezionare **+ Crea una risorsa**.

3. In Azure Marketplace selezionare **Intelligenza artificiale e servizi cognitivi** e individuare l'elenco delle API disponibili. Fare clic su **Vedi tutto** per visualizzare l'intero elenco delle API Servizi cognitivi. Fare clic sull'API scelta per continuare.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Nella pagina **Crea** specificare le informazioni seguenti:

   - **Nome account:** il nome dell'account. È consigliabile usare un nome descrittivo, ad esempio *AFaceAPIAccount*.

   - **Sottoscrizione:** selezionare una delle sottoscrizioni di Azure disponibili in cui si dispone almeno delle autorizzazioni di collaboratore.

   - **Tipo di API:** scegliere l'API Servizi cognitivi da usare. Per altre informazioni sulle varie API Servizi cognitivi a disposizione, visitare la pagina [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/).

   - **Piano tariffario:** il costo dell'account di Servizi cognitivi dipende dall'uso effettivo e dalle opzioni che vengono selezionate. Per altre informazioni in merito ai prezzi per ogni API, fare riferimento alle [pagine sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Gruppo risorse:** un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Per altre informazioni sui gruppi di risorse, vedere [Gestire le risorse di Azure mediante il portale](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Località del gruppo di risorse:** è obbligatorio solo se l'API selezionata è globale (non associata a una posizione). Se l'API è globale e non associata a una posizione, tuttavia, è necessario specificare una posizione per il gruppo di risorse in cui risiedono i metadati associati all'account delle API Servizi cognitivi. Questa posizione non ha alcun effetto sulla disponibilità di runtime pe l'account. Per altre informazioni sul gruppo di risorse, vedere [Gestire le risorse di Azure mediante il portale](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Accettazione esplicita delle condizioni dei servizi online:** per poter creare un account, i proprietari o i collaboratori della sottoscrizione (come definiti dal [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)) devono accettare esplicitamente le condizioni che si applicano ai Servizi cognitivi nelle [Condizioni per l’Utilizzo dei Servizi Online](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Il proprietario della sottoscrizione può disabilitare la creazione dell'account di Servizi cognitivi per una sottoscrizione o un gruppo di risorse specifico tramite i [criteri di Azure](../azure-policy/azure-policy-introduction.md) seguendo l'articolo [Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure](../azure-policy/assign-policy-definition.md) e assegnando un criterio "Tipi di risorse non consentiti" e specificando **Microsoft.CognitiveServices/accounts** come tipo di risorsa di destinazione.

     Se la creazione di account è stata disabilitata, al momento della creazione dovrebbe essere visualizzato l'errore seguente:

     ![Errore di creazione account](media/cognitive-services-apis-create-account/error-message.png)

5. Per aggiungere l'account al dashboard del Portale di Azure, fare clic su **Aggiungi al dashboard**.

6. Fare clic su **Crea** per creare l'account.

Dopo che l'account di Servizi cognitivi è stato distribuito correttamente, fare clic sul riquadro nel dashboard per visualizzare le informazioni sull'account.

È possibile usare l'**URL dell'endpoint** nella sezione **Panoramica** e le chiavi nella sezione **Chiavi** per iniziare a effettuare chiamate alle API nelle applicazioni.

![Mostrare informazioni sull'account](media/cognitive-services-apis-create-account/display-account.png)

![Mostrare le chiavi dell'account](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su tutti i Servizi cognitivi Microsoft a disposizione, vedere [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/).

Per guide introduttive all'uso di alcune API Servizi cognitivi di esempio:

 - [Guide introduttive sull'API Visione artificiale in C#](computer-vision/quickstarts/csharp.md)
 - [Analisi del testo con Python](text-analytics/quickstarts/python.md)
 - [API Viso con JavaScript](face/quickstarts/javascript.md)
