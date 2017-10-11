---
title: Introduzione ad Azure Advisor | Microsoft Docs
description: Introduzione ad Azure Advisor.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Introduzione ad Azure Advisor

Informazioni su come accedere ad Advisor tramite il portale di Azure e ricevere, mettere in pratica, cercare consigli o chiedere consigli aggiornati.

## <a name="get-advisor-recommendations"></a>Ricevere consigli da Advisor

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro sinistro selezionare **Altri servizi**.

3. Nel riquadro del menu del servizio in **Monitoraggio e gestione** fare clic su **Azure Advisor**.  
 Verrà visualizzato il dashboard di Advisor.

   ![Accedere ad Azure Advisor tramite il Portale di Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. Nel dashboard di Advisor selezionare la sottoscrizione per cui si desidera ricevere consigli.  
Nel dashboard di Advisor vengono visualizzati consigli personalizzati per la sottoscrizione selezionata. 

5. Per ricevere consigli per una determinata categoria, fare clic su una delle schede: **Disponibilità elevata**, **Sicurezza**, **Prestazioni** o **Costo**.
 
> [!NOTE]
> Per accedere ai consigli di Advisor, è innanzitutto necessario *registrare la sottoscrizione* con Advisor. Una sottoscrizione viene registrata quando il *proprietario della sottoscrizione* avvia il dashboard di Advisor e fa clic sul pulsante **Ottieni raccomandazioni**. Si tratta di un'*operazione una tantum*. Dopo aver registrato una sottoscrizione, è possibile accedere ai consigli di Advisor come *Proprietario*, *Collaboratore* o *Lettore* di una sottoscrizione, di un gruppo di risorse o di una risorsa specifica.

  ![Dashboard di Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Ottenere i dettagli dei consigli di Advisor e implementare una soluzione

Il pannello **Raccomandazioni** in Advisor offre informazioni aggiuntive sui consigli. 

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi avviare [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Nel dashboard **Raccomandazioni Advisor** fare clic su **Ottieni raccomandazioni**.

3. Nell'elenco dei consigli fare clic su un consigli da esaminare nel dettaglio.  
Verrà visualizzato il pannello **Raccomandazioni**.

4. Nel pannello **Raccomandazioni** esaminare le informazioni sulle azioni che è possibile eseguire per risolvere un potenziale problema o sfruttare un'opportunità per ridurre i costi. 
  
  ![Pannello dei consigli di Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Cercare consigli di Advisor

È possibile cercare consigli per un determinato gruppo di risorse o una determinata sottoscrizione. È anche possibile cercare consigli in base allo stato.

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi avviare [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Cercare i consigli applicando un filtro in base a sottoscrizioni, gruppi di risorse e stato dei consigli (**Attivo** o **Posposto**).

3. Per visualizzare un elenco di consigli di Advisor in base ai criteri dei filtri di ricerca, fare clic su **Ottieni raccomandazioni**.

  ![Criteri dei filtri di ricerca di Advisor](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Posporre o ignorare i consigli di Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi avviare [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Fare clic su **Ottieni raccomandazioni** e quindi fare clic su un consiglio nell'elenco visualizzato.

3. Nel pannello **Consigli** fare clic su **Posponi**.  

   ![Esempio di azione consigliata da Advisor](./media/advisor-get-started/advisor-snooze.png)

4. È possibile specificare il periodo di tempo in base al quale posporre il consiglio oppure selezionare **Mai** per ignorare il consiglio.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
