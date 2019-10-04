---
title: Spostare un progetto di valutazione limitato in Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come spostare un progetto di valutazione limitato in Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: 22c3767dfac1e377890f1e01517d18263e694854
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560922"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Come spostare il progetto di valutazione limitato in Azure

Quando Servizio visione artificiale personalizzato completa il passaggio ad Azure, il supporto per progetti di valutazione limitati al di fuori di Azure sta per scadere. Questo documento illustra come usare le API Visione personalizzata per copiare il progetto di valutazione limitato in una risorsa di Azure.

Il supporto per la visualizzazione di progetti di valutazione limitati sul [sito web visione personalizzata](https://customvision.ai) è terminato il 25 marzo 2019. Questo documento illustra come usare le API Visione personalizzata con uno [script Python di migrazione](https://github.com/Azure-Samples/custom-vision-move-project) in GitHub) per duplicare il progetto in una risorsa di Azure.

Per altri dettagli, incluse le scadenze chiave nel processo di deprecazione della versione di valutazione limitato, fare riferimento alle [Note sulla versione](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) o inviare tramite posta elettronica le comunicazioni ai proprietari dei progetti di valutazione limitati.

Lo [script di migrazione](https://github.com/Azure-Samples/custom-vision-move-project) consente di ricreare un progetto scaricando e caricando tutti i tag, le aree e le immagini nell'iterazione corrente. Verrà lasciato un nuovo progetto nella nuova sottoscrizione, che sarà quindi possibile eseguire il training.

## <a name="prerequisites"></a>Prerequisiti

- Sarà necessaria una sottoscrizione di Azure valida associata all'account account Microsoft o Azure Active Directory (AAD) da usare per accedere al [sito Web di visione personalizzata](https://customvision.ai). 
    - Se non si dispone di un account Azure, [creare un account](https://azure.microsoft.com/free/) gratuito.
    - Per un'introduzione ai concetti relativi ad Azure per sottoscrizioni e risorse, vedere la [Guida per sviluppatori di Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Creare risorse di Visione personalizzata nel portale di Azure

Per usare Servizio visione artificiale personalizzato con Azure, sarà necessario creare Visione personalizzata risorse di formazione e di stima nel [portale di Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

È possibile associare più progetti a una singola risorsa. Sono disponibili maggiori dettagli sui [prezzi e sui limiti](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) . Per continuare a usare Servizio visione artificiale personalizzato gratuitamente, è possibile selezionare il livello F0 nel portale di Azure. 

> [!NOTE]
> Quando si sposta il progetto di Visione personalizzata in una risorsa di Azure, esso eredita le [autorizzazioni]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) sottostanti di tale risorsa di Azure. Se altri utenti dell'organizzazione sono proprietari della risorsa di Azure in cui si trova il progetto, saranno in grado di accedere al progetto nel [sito web visione personalizzata](https://customvision.ai). Analogamente, l'eliminazione delle risorse eliminerà i progetti.  

## <a name="find-your-limited-trial-project-information"></a>Trovare le informazioni sul progetto di valutazione limitato

Per spostare il progetto, saranno necessari l' _ID progetto_ e la _chiave di training_ per il progetto di cui si sta tentando di eseguire la migrazione. Se non si dispone di queste informazioni, visitare [https://limitedtrial.customvision.ai/projects](https://limitedtrial.customvision.ai/projects) per ottenere l'ID e la chiave per ogni progetto. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Usare il codice di esempio Python per copiare il progetto in Azure

Seguire le [istruzioni del codice di esempio](https://github.com/Azure-Samples/custom-vision-move-project), usando la chiave di valutazione limitata e l'ID progetto come materiali di origine e la chiave dalla nuova risorsa di Azure creata come destinazione.

Per impostazione predefinita, tutti i progetti di valutazione limitati sono ospitati nell'area di Azure Stati Uniti centro-meridionali.

## <a name="next-steps"></a>Passaggi successivi

Il progetto è stato ora spostato in una risorsa di Azure. Sarà necessario aggiornare le chiavi di training e di stima in tutte le applicazioni scritte.

Per visualizzare il progetto nel [sito web visione personalizzata](https://customvision.ai), accedere con lo stesso account usato per accedere al portale di Azure. Se il progetto non è visibile, verificare che l'utente si trovi nella stessa directory del [sito web visione personalizzata](https://customvision.ai) come la directory in cui si trovano le risorse nel portale di Azure. Sia nella portale di Azure che nel CustomVision.ai è possibile selezionare la directory dal menu utente a discesa nell'angolo in alto a destra della schermata.