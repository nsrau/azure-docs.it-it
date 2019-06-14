---
title: Eliminare un'area di lavoro Azure Log Analytics | Microsoft Docs
description: Informazioni su come eliminare l'area di lavoro Log Analytics, se ne è stata creata una in una sottoscrizione personale, o come ristrutturare il modello dell'area di lavoro.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: a6542838acba3143123dc90d96746179a2b4469b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236105"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Eliminare un'area di lavoro Azure Log Analytics con il portale di Azure
Questo articolo descrive come usare il portale di Azure per eliminare un'area di lavoro Log Analytics non più necessaria. 

## <a name="to-delete-a-workspace"></a>Per eliminare un'area di lavoro 
Quando si elimina un'area di lavoro Log Analytics, tutti i dati relativi a tale area vengono eliminati dal servizio entro 30 giorni.  Prestare particolare attenzione quando si elimina un'area di lavoro perché potrebbero essere presenti dati e configurazioni importanti che possono influire negativamente sulle operazioni del servizio. Prendere in considerazione gli altri servizi e origini di Azure che archiviano i dati in Log Analytics, ad esempio:

* Application Insights
* Centro sicurezza di Azure
* Automazione di Azure
* Agenti in esecuzione in macchine virtuali Windows e Linux
* Agenti in esecuzione in computer Windows e Linux nell'ambiente
* System Center Operations Manager
* Soluzioni di gestione 

Tutti gli agenti e i gruppi di gestione di System Center Operations Manager configurati per l'invio di informazioni all'area di lavoro continuano in uno stato orfano.  Creare un inventario degli agenti, delle soluzioni e di altri servizi di Azure integrati con l'area di lavoro prima di procedere.   
 
Se si è un amministratore e vi sono più utenti associati all'area di lavoro, l'associazione tra quest'ultima e gli utenti viene interrotta. Se gli utenti sono associati ad altre aree di lavoro, potranno continuare a usare Log Analytics con tali aree di lavoro. Tuttavia, se non sono associati ad altre aree di lavoro, per usare Log Analytics devono creare una nuova area di lavoro. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
3. Nel riquadro delle sottoscrizioni di Log Analytics selezionare un'area di lavoro e quindi fare clic su **Elimina** nella parte superiore del riquadro centrale.<br><br> ![Delete option from Workspace properties pane](media/delete-workspace/log-analytics-delete-workspace.png) (Opzione Elimina dal riquadro delle proprietà dell'area di lavoro)<br>  
4. Fare clic su **Sì** nella finestra di messaggio di conferma visualizzata che richiede di confermare l'eliminazione dell'area di lavoro.<br><br> ![Confermare l'eliminazione dell'area di lavoro](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

