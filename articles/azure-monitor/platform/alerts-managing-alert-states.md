---
title: Gestire gli stati di avvisi e gruppi intelligenti
description: Gestione degli stati delle istanze degli avvisi e dei gruppi intelligenti
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60919662"
---
# <a name="manage-alert-and-smart-group-states"></a>Gestire gli stati di avvisi e gruppi intelligenti
Gli avvisi in Monitoraggio di Azure ora dispongono di uno [stato di avviso e di una condizione di monitoraggio](https://aka.ms/azure-alerts-overview) e, analogamente, i gruppi intelligenti dispongono di uno [stato del gruppo intelligente](https://aka.ms/smart-groups). Le modifiche allo stato ora vengono acquisite nella cronologia associata al rispettivo gruppo di avvisi o gruppo intelligente. Questo articolo illustra il processo di modifica dello stato per un avviso e per un gruppo intelligente.

## <a name="change-the-state-of-an-alert"></a>Modificare lo stato di un avviso
1. È possibile modificare lo stato di un avviso nei diversi modi seguenti: 
    * Nella pagina Tutti gli avvisi selezionare la casella di controllo accanto agli avvisi di cui si desidera modificare lo stato e fare clic su Cambia stato.   
    ![Monitoraggio](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Nella pagina Dettagli avviso di una particolare istanza di avviso è possibile fare clic su Cambia stato   
    ![Monitoraggio](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Nella pagina Dettagli avviso di una particolare istanza di avviso nel riquadro Gruppo intelligente è possibile fare clic sulla casella di controllo accanto agli avvisi desiderati    
    ![Monitoraggio](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Nella pagina dei dettagli del gruppo intelligente nell'elenco degli avvisi dei membri è possibile fare clic sulla casella di controllo accanto agli avvisi di cui si desidera modificare lo stato di e fare clic su Cambia stato per modificare lo stato.   
    ![Monitoraggio](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Facendo clic su Cambia stato si apre una finestra popup che consente di selezionare lo stato (Nuovo/Confermato/Chiuso) e immettere un commento, se necessario.   
![Monitoraggio](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Al termine, la modifica dello stato viene registrata nella cronologia dell'avviso corrispondente. È possibile visualizzare ciò aprendo la pagina Dettagli corrispondente e controllando la sezione della cronologia.    
![Monitoraggio](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Modificare lo stato di un gruppo intelligente
1. È possibile modificare lo stato di un gruppo intelligente nei diversi modi seguenti:
    1. Nella pagina dell'elenco del gruppo intelligente fare clic sulla casella di controllo accanto ai gruppi intelligenti di cui si desidera modificare lo stato e fare clic su Cambia stato  
    ![Monitoraggio](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Nella pagina dei dettagli del gruppo intelligente è possibile fare clic su Cambia stato        
    ![Monitoraggio](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Facendo clic su Cambia stato si apre una finestra popup che consente di selezionare lo stato (Nuovo/Confermato/Chiuso) e immettere un commento, se necessario. 
![Monitoraggio](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  La modifica dello stato di un gruppo intelligente non modifica lo stato degli avvisi dei membri individuali.

1. Al termine, la modifica dello stato viene registrata nella cronologia del gruppo intelligente corrispondente. È possibile visualizzare ciò aprendo la pagina Dettagli corrispondente e controllando la sezione della cronologia.     
![Monitoraggio](./media/alerts-managing-alert-states/state-sg-history.jpg)

