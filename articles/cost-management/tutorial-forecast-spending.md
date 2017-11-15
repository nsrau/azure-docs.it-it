---
title: Previsione della spesa in Gestione dei costi di Azure | Microsoft Docs
description: Previsione della spesa mediante l'utilizzo cronologico e dati di spesa.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: d8b0cd2a3e5f9829f0844783aad22d375eb9d7a8
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="forecast-future-spending"></a>Previsione della spesa futura

Gestione dei costi di Azure di Cloudyn consente di prevedere la spesa futura tramite l'utilizzo cronologico e dati di spesa. Usare i report di Cloudyn per visualizzare tutti i dati di previsione dei costi. Gli esempi inclusi in questa esercitazione descrivono come esaminare le previsioni dei costi usando i report. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Prevedere la spesa futura

## <a name="forecast-future-spending"></a>Previsione della spesa futura

Cloudyn include report di previsione dei costi che consentono di prevedere la spesa in base all'utilizzo nel tempo. Lo scopo principale consiste nel garantire che le tendenze dei costi non superino le previsioni dell'organizzazione. I report usati sono Current Month Projected Cost (Previsione costi mese corrente) e Annual Projected Cost (Previsione costi annuali). Entrambi indicano la previsione della spesa se l'utilizzo rimane relativamente coerente con quello degli ultimi 30 giorni.

Il report Current Month Projected Cost (Previsione costi mese corrente) mostra i costi dei servizi. Usa i costi dall'inizio del mese e quelli del mese precedente per visualizzare il costo previsto. Dal menu dei report nella parte superiore del portale, fare clic su **Cost** > **Projection and Budget** > **Current Month Projected Cost** (Costo\Previsione e budget\Previsione costi mese corrente). La figura seguente mostra un esempio.

![Current Month Projected Cost (Previsione costi mese corrente)](./media/tutorial-forecast-spending/project-month01.png)

Nell'esempio è possibile visualizzare i servizi maggiormente costosi. I costi di Azure sono inferiori ai costi AWS. Se si desidera visualizzare i dettagli di previsione di costo per le macchine virtuali di Azure, nell'elenco **Filter** (Filtro) selezionare **Azure/VM** (Macchina virtuale di Azure).

![Previsione costi mese corrente per le macchine virtuali di Azure](./media/tutorial-forecast-spending/project-month02.png)

Seguire la stessa procedura di base precedente per visualizzare le proiezioni di costo mensile per gli altri servizi di interesse.

Annual Projected Cost (Previsione costi annuali) mostra il costo estrapolato dei servizi nei prossimi 12 mesi.

Dal menu dei report nella parte superiore del portale, fare clic su **Cost** > **Projection and Budget** > **Annual Projected Cost** (Costo\Previsione e budget\Previsione costi annuali). La figura seguente mostra un esempio.

![report Annual Projected Cost (Previsione costi annuali)](./media/tutorial-forecast-spending/project-annual01.png)

Nell'esempio è possibile visualizzare i servizi maggiormente costosi. Come illustrato nell'esempio mensile, i costi di Azure sono stati inferiori ai costi AWS. Se si desidera visualizzare i dettagli di previsione di costo per le macchine virtuali di Azure, nell'elenco **Filter** (Filtro) selezionare **Azure/VM** (Macchina virtuale di Azure).

![Previsione dei costi annuali per le macchine virtuali di Azure](./media/tutorial-forecast-spending/project-annual02.png)

Nell'immagine precedente il costo previsto annuo delle macchine virtuali di Azure è 28.374 dollari.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Prevedere la spesa futura


Passare alla prossima esercitazione per imparare a gestire i costi con l'allocazione costi e i report di showback.

> [!div class="nextstepaction"]
> [Gestire i costi con allocazione costi e report di showback](tutorial-manage-costs.md)
