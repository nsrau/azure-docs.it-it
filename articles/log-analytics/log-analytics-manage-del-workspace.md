---
title: Eliminare un'area di lavoro di Azure Log Analytics | Microsoft Docs
description: "Informazioni su come eliminare l'area di lavoro di Log Analytics, se ne è stata creata una in una sottoscrizione personale, o come ristrutturare il modello dell'area di lavoro."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Eliminare un'area di lavoro di Azure Log Analytics con il portale di Azure
Questo argomento descrive come usare il portale di Azure per eliminare un'area di lavoro di Log Analytics non più necessaria. 

## <a name="to-delete-a-workspace"></a>Per eliminare un'area di lavoro 
Quando si elimina un'area di lavoro di Log Analytics, tutti i dati relativi a tale area vengono eliminati dal servizio entro 30 giorni.  Prestare particolare attenzione quando si elimina un'area di lavoro perché potrebbero essere presenti dati e configurazioni importanti che possono influire negativamente sulle operazioni del servizio.  
 
Se si è un amministratore e vi sono più utenti associati all'area di lavoro, l'associazione tra quest'ultima e gli utenti viene interrotta. Se gli utenti sono associati ad altre aree di lavoro, potranno continuare a usare Log Analytics con tali aree di lavoro. Tuttavia, se non sono associati ad altre aree di lavoro, per usare Log Analytics devono creare una nuova area di lavoro. 

1. Accedere al [portale di Azure](http://portal.azure.com). 
2. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
3. Nel riquadro delle sottoscrizioni di Log Analytics selezionare un'area di lavoro e quindi fare clic su **Elimina** nella parte superiore del riquadro centrale.<br><br> ![Delete option from Workspace properties pane](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png) (Opzione Elimina dal riquadro delle proprietà dell'area di lavoro)<br>  
4. Fare clic su **Sì** nella finestra di messaggio di conferma visualizzata che richiede di confermare l'eliminazione dell'area di lavoro.<br><br> ![Confermare l'eliminazione dell'area di lavoro](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

