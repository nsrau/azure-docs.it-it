---
title: Eseguire query tra le aree di lavoro in Azure Log Analytics | Microsoft Docs
description: "Questo articolo illustra come eseguire query tra più aree di lavoro nella sottoscrizione e fornisce degli esempi."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Eseguire query tra le aree di lavoro in Azure Log Analytics

Precedentemente con Azure Log Analytics era possibile analizzare i dati solo all'interno dell'area di lavoro corrente. Questo aspetto limitava la capacità di eseguire query tra più aree di lavoro definite nella sottoscrizione.  

Ora è possibile eseguire query tra più aree di lavoro e avere a disposizione una visualizzazione dei dati dell'intero sistema.  È possibile eseguire questo tipo di query solo in [Advanced portal](log-analytics-log-search-portals.md#advanced-analytics-portal) (Portale avanzato) e non nel portale di Azure.  

## <a name="querying-across-log-analytics-workspaces"></a>Esecuzione di query tra aree di lavoro di Log Analytics
Per fare riferimento a un'altra area di lavoro nella query, usare l'identificatore dell'*area di lavoro*.  Ad esempio, la query seguente restituisce i conteggi riepilogati degli aggiornamenti necessari in base alla classificazione nella tabella di aggiornamento sia dell'area di lavoro corrente che dell'altra area di lavoro denominata *contosoretail-it*.  


## <a name="identifying-resources"></a>Identificazione delle risorse
Esistono vari modi per identificare un'area di lavoro:

* Nome risorsa - Un nome dell'area di lavoro leggibile dall'utente, spesso definito genericamente *nome del componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >L'identificazione di un'area di lavoro in base al nome presuppone che il nome sia univoco in tutte le sottoscrizioni accessibili. Se sono presenti più applicazioni con il nome specificato, la query avrà esito negativo a causa dell'ambiguità. In questo caso è necessario usare uno degli altri identificatori.

* Nome completo - È il nome completo dell'area di lavoro, composto dal nome della sottoscrizione, dal gruppo di risorse e dal nome del componente nel formato seguente: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Poiché i nomi delle sottoscrizioni di Azure non sono univoci, questo identificatore potrebbe essere ambiguo. 
    >

* ID area di lavoro - L'ID dell'area di lavoro è l'identificatore univoco e non modificabile assegnato a ogni area di lavoro rappresentato come un identificatore univoco globale (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* ID risorsa Azure - L'identità univoca definita da Azure dell'area di lavoro. Si usa quando il nome della risorsa è ambiguo.  Per le aree di lavoro, il formato è: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    ad esempio:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Passaggi successivi

Vedere il [riferimento alla ricerca nei log di Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) per visualizzare tutte le opzioni della sintassi di query disponibili in Log Analytics.    