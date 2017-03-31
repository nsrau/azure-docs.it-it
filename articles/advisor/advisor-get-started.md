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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 8ed18f6f62b6d2895e2470df1c3bdd448ea98210
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-with-azure-advisor"></a>Introduzione ad Azure Advisor

Informazioni su come accedere ad Advisor tramite il Portale di Azure e ricevere, mettere in pratica, cercare consigli o chiedere consigli aggiornati.

## <a name="get-advisor-recommendations"></a>Ricevere consigli da Advisor

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Nel riquadro di navigazione sinistro fare clic su **More services** (Altri servizi), poi nel riquadro con il menu dei servizi scorrere verso il basso fino a **Monitoring and Management** (Monitoraggio e gestione), quindi fare clic su **Azure Advisor**. Viene avviato il dashboard di Advisor.

  ![Accedere ad Azure Advisor tramite il Portale di Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. Nel dashboard di Advisor selezionare la sottoscrizione per cui si desidera ricevere consigli. Nel dashboard di Advisor vengono visualizzati consigli personalizzati per la sottoscrizione selezionata. 
4. Per ricevere consigli per una determinata categoria, fare clic su una delle schede (**Disponibilità elevata**, **Sicurezza**, **Prestazioni** o **Costo**).
 
> [!NOTE]
> Per accedere alle raccomandazioni di Advisor, è innanzitutto necessario **registrare** la sottoscrizione con Advisor. Una sottoscrizione viene registrata quando il **proprietario della sottoscrizione** avvia il dashboard di Advisor e fa clic sul pulsante **Get recommendations** (Ottieni consigli). Si tratta di un'**operazione una tantum**. Dopo aver registrato una sottoscrizione, è possibile accedere alle raccomandazioni di Advisor come **Proprietario**, **Collaboratore** o **Lettore** di una sottoscrizione, di un gruppo di risorse o di una risorsa specifica.

  ![Dashboard di Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>Ricevere e mettere in pratica consigli di Advisor

Il pannello **Consigli** di Advisor offre informazioni approfondite sul consiglio. 

1. Accedere al [portale di Azure](https://portal.azure.com), quindi avviare [Azure Advisor](https://aka.ms/azureadvisordashboard).
2. Nel dashboard **Advisor recommendations** (Consigli di Advisor) fare clic su **Get Recommendation** (Ricevi consiglio).
3. Dall'elenco di consigli, fare clic su un consiglio che si desidera esaminare in dettaglio. Viene avviato il pannello dei consigli.
4. Nel pannello dei consigli esaminare le informazioni sulle azioni che è possibile eseguire per risolvere un potenziale problema o sfruttare un'opportunità per ridurre i costi. 
  
  ![Esempio di azione consigliata da Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Cercare consigli di Advisor

È possibile cercare consigli per un determinato gruppo di risorse o una determinata sottoscrizione. È anche possibile cercare consigli in base allo stato.

1. Accedere al portale di Azure, quindi avviare Azure Advisor.
2. Cercare i consigli applicando un filtro in base a sottoscrizioni, gruppi di risorse e stato dei consigli (**Attivo** o **Posposto**).
3. Fare clic su **Get recommendations** (Ricevi consigli) per ricevere un elenco di consigli di Advisor sulla base dei filtri di ricerca.

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>Posporre i consigli di Advisor

1. Accedere al portale di Azure, quindi avviare Azure Advisor.
2. Dall'elenco di consigli fare clic su **Get Recommendation** (Ricevi consiglio), quindi selezionare un consiglio.
3. Nel pannello **Consigli** fare clic su **Posponi**.  È possibile specificare fino a quando posporre oppure selezionare **Mai** per ignorare il consiglio.

  ![Esempio di azione consigliata da Advisor](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Advisor, vedere le risorse seguenti:
-  [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
-  [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
-  [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
-  [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)

