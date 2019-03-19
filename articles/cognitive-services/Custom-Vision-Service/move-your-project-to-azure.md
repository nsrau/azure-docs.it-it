---
title: Spostare un progetto di valutazione limitato in Azure
titlesuffix: Azure Cognitive Services
description: Informazioni su come spostare un progetto di versione di valutazione limitata in Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821309"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Come spostare il progetto della versione di valutazione limitata in Azure usando il sito CustomVision.ai


Servizio visione artificiale personalizzato è ora nella [anteprima Azure](https://azure.microsoft.com/services/preview/), supporto per i progetti di versione di valutazione limitata all'esterno di Azure sta terminando. Questo documento viene illustrato come usare il [sito Web di Custom Vision](https://customvision.ai) per spostare il progetto della versione di valutazione limitata da associare a una risorsa di Azure. 

> [!NOTE]
> Quando si spostano i progetti di visione artificiale personalizzato a una risorsa di Azure, eredita sottostante [autorizzazioni]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di tale risorsa di Azure. Se altri utenti nell'organizzazione sono proprietari della risorsa è il progetto in Azure, saranno in grado di accedere al progetto nel [sito Web di Custom Vision](https://customvision.ai). Analogamente, l'eliminazione delle risorse eliminerà i progetti.  


Per un'introduzione ai concetti di sottoscrizioni e risorse di Azure, vedere il [Guida per sviluppatori di Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Prerequisiti

È necessaria una sottoscrizione di Azure valida associata alla stesso account Microsoft o account di Azure Active Directory (AAD) usato per accedere al [sito Web di Custom Vision](https://customvision.ai). 

Se non hai un account Azure, [creare un account](https://azure.microsoft.com/free/) gratuitamente.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Creare le risorse di visione artificiale personalizzato nel portale di Azure
Per utilizzare servizio visione artificiale personalizzato con Azure, è necessario creare risorse di Training di Custom Vision e stima il [portale di Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Per spostare il progetto utilizzando questo [sito Web di Custom Vision](https://customvision.ai) esperienza, è necessario creare le risorse nell'area South Central US, perché tutti i progetti di versione di valutazione limitata sono ospitati in South Central US. 

Più progetti possono essere associati a una singola risorsa. Più in dettaglio [prezzi e limiti](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) è disponibile. Per continuare a usare gratuitamente servizio visione artificiale personalizzato, è possibile selezionare il livello F0 nel portale di Azure. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Spostare il progetto della versione di valutazione limitata a una risorsa di Azure

1.  Nel web browser, passare al [sito Web di Custom Vision](https://customvision.ai) e selezionare __Accedi__. Aprire il progetto che si desidera eseguire la migrazione a un account Azure. 
2.  Aprire la pagina di impostazioni per il progetto facendo clic sull'icona a forma di ingranaggio nell'angolo superiore destra della schermata. 

    ![Le impostazioni del progetto è l'icona a forma di ingranaggio in alto a destra della pagina del progetto.](./media/move-your-project-to-azure/settings-icon.png)


3. Fare clic su __spostare in Azure__.

    ![Passare al pulsante di Azure è a sinistra nella parte inferiore della pagina Impostazioni di progetto.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Nell'elenco a discesa nel __spostare in Azure__ pulsante, selezionare la risorsa di Azure che si desidera spostare il progetto. Fare clic su __spostare__. 

5. Se non viene visualizzata la risorsa di Azure che per il servizio visione artificiale personalizzato creato in precedenza, potrebbe essere in un'altra directory. Per spostare il progetto a una risorsa in un'altra directory, seguire le istruzioni seguenti. 

    ![Finestra di migrazione di progetti.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>Sposta progetto in un'altra directory di Azure 

> [!NOTE]
> Nel portale di Azure sia CustomVision.ai, è possibile selezionare la directory dal menu utente elenco a discesa nell'angolo superiore destro dello schermo.   


1. Identificare quali le risorse di Azure sono in una directory. È possibile trovare la directory indicata sotto il nome utente in alto a destra della barra dei menu del portale di Azure. 

    ![La directory è elencata sotto il nome utente in alto a destra della barra dei menu del portale di Azure. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Trovare l'ID risorsa della risorsa di formazione di visione artificiale personalizzato. È possibile trovarlo nel portale di Azure, aprire la risorsa di formazione di visione artificiale personalizzato e selezionare "Proprietà" nella sezione "Gestione delle risorse". L'ID di risorsa sarà presente. 

    ![Trovare l'ID risorsa nel portale di Azure, aprire la risorsa di formazione di visione artificiale personalizzato e selezionare "Proprietà" nella sezione "Gestione delle risorse".](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. In alternativa, è possibile trovare l'ID risorsa della risorsa di visione artificiale personalizzato direttamente nel sito Web di Custom Vision [pagina Impostazioni]( https://www.customvision.ai/projects#/settings). È necessario passare alla directory stessa in che risorsa di Azure è.

    ![Viene elencato l'ID di risorsa per ogni risorsa nella pagina Impostazioni sul sito Web di visione artificiale personalizzato.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Dopo aver creato l'ID di risorsa, tornare al progetto visione artificiale personalizzato che si sta tentando di passare da una versione di valutazione limitata a una risorsa di Azure. Promemoria, potrebbe essere necessario tornare alla directory originale per trovarlo. Seguire le istruzioni fornite [sopra](#move-your-limited-trial-project-to-an-azure-resource) per aprire la pagina di impostazioni di progetto e selezionare __spostare in Azure__. 


5. Durante lo spostamento alla finestra di Azure, selezionare la casella per "Sposta in un'altra directory di Azure?". Selezionare la directory in cui che si desidera spostare il progetto e immettere l'ID risorsa della risorsa di cui che si sta spostando il progetto. Fare clic su __spostare__. 



5. Tenere presente che il progetto è ora in una directory diversa. Per trovare il progetto, è necessario passare alla stessa directory nel portale web di visione artificiale personalizzato che il progetto si trova in. In entrambi il portale di Azure e il [sito Web di Custom Vision](https://customvision.ai), è possibile selezionare la directory dal menu dell'account di elenco a discesa nell'angolo superiore destro dello schermo. 

## <a name="next-steps"></a>Passaggi successivi

A questo punto il progetto è stato spostato a una risorsa di Azure. È necessario aggiornare le chiavi di Training e previsione in tutte le applicazioni che è stato scritto.
