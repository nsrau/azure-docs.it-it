---
title: "Procedura: Come distribuire un servizio Web in più aree | Documentazione Microsoft"
description: Procedura per distribuire (copiare) un nuovo servizio Web in altre aree.
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl
ms.openlocfilehash: 58c0f5b0ac8e96c2457db4b86dcf2483f68289c6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Procedura: Come distribuire un servizio Web in più aree
I nuovi servizi Web di Azure consentono di distribuire facilmente un servizio Web in più aree, senza la necessità di più sottoscrizioni o aree di lavoro. 

I prezzi sono specifici per ogni area. È quindi necessario definire un piano di fatturazione per ogni area in cui verrà distribuito il servizio Web.

## <a name="to-create-a-plan-in-another-region"></a>Per creare un piano in un'altra area
1. Accedere a [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/)(Servizi Web Microsoft Azure Machine Learning).
2. Fare clic sull'opzione del menu **Plans** (Piani).
3. Nella pagina della panoramica Plans (Piani), fare clic su **New**(Nuovo).
4. Nell'elenco a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione in cui risiederà il nuovo piano.
5. Nell'elenco a discesa **Regione** (Area) selezionare un'area per il nuovo piano. Le opzioni del piano per l'area selezionata verranno visualizzate nella sezione **Plan Options** (Opzioni del piano) della pagina.
6. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare un gruppo di risorse per il piano. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. In **Plan Name** (Nome piano) digitare il nome del piano.
8. In **Plan Options**(Opzioni piano) selezionare il livello di fatturazione per il nuovo piano.
9. Fare clic su **Crea**.

## <a name="deploying-the-web-service-to-another-region"></a>Distribuzione di un servizio Web in un'altra area
1. Fare clic sull'opzione del menu **Web Services** (Servizi Web).
2. Selezionare il servizio Web da distribuire in una nuova area.
3. Fare clic su **Copy**.
4. In **Web Service Name**(Nome servizio Web) digitare un nuovo nome per il servizio Web.
5. In **Web service description**(Descrizione servizio Web) immettere una descrizione per il servizio Web.
6. Nell'elenco a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione in cui risiederà il nuovo servizio Web.
7. Nell'elenco a discesa **Resource Group** (Gruppo di risorse) selezionare un gruppo di risorse per il servizio Web. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Nell'elenco a discesa **Region** (Area) selezionare l'area in cui si desidera distribuire il servizio Web.
9. Nell'elenco a discesa **Storage account** (Account di archiviazione) selezionare un account di archiviazione in cui archiviare il servizio Web.
10. Nell'elenco a discesa **Price Plan** (Piano tariffario) selezionare un piano nell'area scelta nel passaggio 8.
11. Fare clic su **Copy**.

