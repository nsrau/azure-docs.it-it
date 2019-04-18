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
ms.openlocfilehash: 6fac6531ea0a39796de13f95aee33b30dc91f131
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274451"
---
# <a name="how-to-move-your-limited-trial-project-to-azure"></a>Come spostare il progetto della versione di valutazione limitata in Azure

Come servizio visione artificiale personalizzato viene completato il passaggio in Azure, supporto per i progetti di versione di valutazione limitata all'esterno di Azure sta terminando. Questo documento verrà illustrato come usare le API visione artificiale personalizzato per copiare il progetto della versione di valutazione limitata a una risorsa di Azure.

Supporto per la visualizzazione dei progetti di versione di valutazione limitata nel [sito Web di Custom Vision](https://customvision.ai) è terminato il 25 marzo 2019. Questo documento viene ora illustrato come utilizzare le API visione artificiale personalizzato con un [script di python migrazione](https://github.com/Azure-Samples/custom-vision-move-project) su GitHub) per duplicare il progetto per una risorsa di Azure.

Per altri dettagli, comprese le scadenze di chiavi nel processo di deprecazione di valutazione limitata, consultare il [note sulla versione](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/release-notes#february-25-2019) o per le comunicazioni di posta elettronica inviate ai proprietari dei progetti di valutazione limitati.

Il [script di migrazione](https://github.com/Azure-Samples/custom-vision-move-project) consente di ricreare un progetto scaricando e caricando quindi tutti i tag, aree e le immagini nell'iterazione corrente. Si rimarrà con un nuovo progetto nella tua nuova sottoscrizione che è quindi possibile eseguire il training.

## <a name="prerequisites"></a>Prerequisiti

- È necessaria una sottoscrizione Azure valida associata all'account Microsoft o account di Azure Active Directory (AAD) da usare per accedere al [sito Web di Custom Vision](https://customvision.ai). 
    - Se non hai un account Azure, [creare un account](https://azure.microsoft.com/free/) gratuitamente.
    - Per un'introduzione ai concetti di sottoscrizioni e risorse di Azure, vedere la [Guida per sviluppatori di Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions).
-  [Python](https://www.python.org/downloads/)
- [Pip](https://pip.pypa.io/en/stable/installing/)

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Creare le risorse di visione artificiale personalizzato nel portale di Azure

Per utilizzare servizio visione artificiale personalizzato con Azure, è necessario creare risorse di Training di Custom Vision e stima il [portale di Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

Più progetti possono essere associati a una singola risorsa. Più in dettaglio [prezzi e limiti](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) è disponibile. Per continuare a usare gratuitamente servizio visione artificiale personalizzato, è possibile selezionare il livello F0 nel portale di Azure. 

> [!NOTE]
> Quando si sposta il progetto di visione artificiale personalizzato a una risorsa di Azure, eredita l'oggetto sottostante [autorizzazioni]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di tale risorsa di Azure. Se altri utenti nell'organizzazione sono proprietari della risorsa è il progetto in Azure, saranno in grado di accedere al progetto nel [sito Web di Custom Vision](https://customvision.ai). Analogamente, l'eliminazione delle risorse eliminerà i progetti.  

## <a name="find-your-limited-trial-project-information"></a>Trovare le informazioni sul progetto di valutazione limitata

Per spostare il progetto, è necessario il _ID progetto_ e _chiave training_ per il progetto si sta tentando di eseguire la migrazione. Se non si hanno queste informazioni, visitare [ https://limitedtrial.customvision.ai/projects ](https://limitedtrial.customvision.ai/projects) per ottenere l'ID e la chiave per ciascuno dei progetti. 

## <a name="use-the-python-sample-code-to-copy-your-project-to-azure"></a>Usare il codice Python di esempio per copiare il progetto in Azure

Seguire le [istruzioni di codice di esempio](https://github.com/Azure-Samples/custom-vision-move-project), usando la chiave di valutazione limitata e ID di progetto come i materiali di "origine" e la chiave dalla nuova risorsa di Azure è stato creato come "destination".

Per impostazione predefinita, tutti i progetti di versione di valutazione limitata sono ospitati nell'area South Central US Azure.

## <a name="next-steps"></a>Passaggi successivi

A questo punto il progetto è stato spostato a una risorsa di Azure. È necessario aggiornare le chiavi di Training e previsione in tutte le applicazioni che è stato scritto.

Per visualizzare il progetto sul [sito Web di Custom Vision](https://customvision.ai), accedere con lo stesso account usato per accedere al portale di Azure. Se non viene visualizzato il progetto, verificare che siano nella stessa directory nel [sito Web di Custom Vision](https://customvision.ai) come la directory in cui si trovano le risorse nel portale di Azure. Nel portale di Azure sia CustomVision.ai, è possibile selezionare la directory dal menu utente elenco a discesa nell'angolo superiore destro dello schermo.