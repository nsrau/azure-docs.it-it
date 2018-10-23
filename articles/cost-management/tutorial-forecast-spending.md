---
title: Esercitazione - Previsione della spesa con Cloudyn in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come prevedere la spesa tramite dati cronologici di utilizzo e spesa.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: e1954ae9fcbb010c66e8a812e3e34477ac2bfc29
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395225"
---
# <a name="tutorial-forecast-future-spending"></a>Esercitazione: Prevedere la spesa futura

Cloudyn consente di prevedere la spesa futura basandosi sui dati cronologici di utilizzo e spesa. Usare i report di Cloudyn per visualizzare tutti i dati di previsione dei costi. Gli esempi inclusi in questa esercitazione descrivono come esaminare le previsioni dei costi usando i report. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Prevedere la spesa futura

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- È necessario disporre di un account Azure.
- È necessario disporre di una registrazione di valutazione o una sottoscrizione a pagamento per Cloudyn.

## <a name="forecast-future-spending"></a>Prevedere la spesa futura

Cloudyn include report di previsione dei costi che consentono di prevedere la spesa in base all'utilizzo nel tempo. Lo scopo principale consiste nel garantire che le tendenze dei costi non superino le previsioni dell'organizzazione. I report usati sono Current Month Projected Cost (Previsione costi mese corrente) e Annual Projected Cost (Previsione costi annuali). Entrambi indicano la previsione della spesa se l'utilizzo rimane relativamente coerente con quello degli ultimi 30 giorni.

Il report Current Month Projected Cost (Previsione costi mese corrente) mostra i costi dei servizi. Usa i costi dall'inizio del mese e quelli del mese precedente per visualizzare il costo previsto. Dal menu dei report nella parte superiore del portale fare clic su **Costs** > **Projection and Budget** > **Current Month Projected Cost** (Costi\Previsione e budget\Previsione costi mese corrente). La figura seguente mostra un esempio.

![Previsione costi mese corrente](./media/tutorial-forecast-spending/project-month01.png)

Nell'esempio è possibile visualizzare i servizi maggiormente costosi. I costi di Azure sono inferiori ai costi AWS. Se si desidera visualizzare i dettagli di previsione di costo per le macchine virtuali di Azure, nell'elenco **Filter** (Filtro) selezionare **Azure/VM** (Macchina virtuale di Azure).

![Previsione costi mese corrente per le macchine virtuali di Azure](./media/tutorial-forecast-spending/project-month02.png)

Seguire la stessa procedura di base precedente per visualizzare le proiezioni di costo mensile per gli altri servizi di interesse.

Annual Projected Cost (Previsione costi annuali) mostra il costo estrapolato dei servizi nei prossimi 12 mesi.

Dal menu dei report nella parte superiore del portale fare clic su **Costs** > **Projection and Budget** > **Annual Projected Cost** (Costi\Previsione e budget\Previsione costi annuali). La figura seguente mostra un esempio.

![Report Previsione costi annuali](./media/tutorial-forecast-spending/project-annual01.png)

Nell'esempio è possibile visualizzare i servizi maggiormente costosi. Come illustrato nell'esempio mensile, i costi di Azure sono stati inferiori ai costi AWS. Se si desidera visualizzare i dettagli di previsione di costo per le macchine virtuali di Azure, nell'elenco **Filter** (Filtro) selezionare **Azure/VM** (Macchina virtuale di Azure).

![Previsione dei costi annuali per le macchine virtuali di Azure](./media/tutorial-forecast-spending/project-annual02.png)

Nell'immagine precedente il costo previsto annuo delle macchine virtuali di Azure è 28.374 dollari.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Prevedere la spesa futura


Passare alla prossima esercitazione per imparare a gestire i costi con l'allocazione costi e i report di showback.

> [!div class="nextstepaction"]
> [Gestire i costi con allocazione costi e report di showback](tutorial-manage-costs.md)
