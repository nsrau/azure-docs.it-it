---
title: Usare il portale di Azure per monitorare un'applicazione gestita | Microsoft Docs
description: Viene mostrato come usare il portale di Azure per monitorare disponibilità e avvisi per un'applicazione gestita.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 754d2a246a86585e9f05f8a070c51e158f73affd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253645"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorare un'istanza distribuita di un'applicazione gestita

Dopo aver distribuito un'applicazione gestita alla sottoscrizione di Azure, è possibile controllare lo stato dell'applicazione. Questo articolo mostra le opzioni per verificare lo stato all'interno del portale di Azure. È possibile monitorare la disponibilità delle risorse nell'applicazione gestita. È anche possibile configurare e visualizzare gli avvisi.

## <a name="view-resource-health"></a>Visualizzare lo stato di integrità delle risorse

1. Selezionare l'istanza dell'applicazione gestita.

   ![Selezionare l'applicazione gestita](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selezionare **Integrità risorse**.

   ![Selezionare l'integrità risorse](./media/monitor-managed-application-portal/select-resource-health.png)

1. Visualizzare la disponibilità delle risorse nell'applicazione gestita.

   ![Visualizzare lo stato di integrità delle risorse](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Visualizzare gli avvisi

1. Selezionare **Avvisi**.

   ![Selezionare gli avvisi](./media/monitor-managed-application-portal/select-alerts.png)

1. Se le regole di avviso sono state configurate, vengono visualizzate le informazioni sugli avvisi generati.

   ![Visualizzare gli avvisi](./media/monitor-managed-application-portal/view-alerts.png)

1. Per aggiungere le regole di avviso, selezionare **+ Nuova regola di avviso**.

   ![Creare un avviso](./media/monitor-managed-application-portal/create-new-alert.png)

È possibile creare avvisi per l'istanza di applicazione gestita o risorse nell'applicazione gestita. Per informazioni sulla creazione di avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* Per esempi di applicazioni gestite, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).
* Per distribuire un'istanza di applicazione gestita, vedere [Deploy service catalog app through Azure portal](deploy-service-catalog-quickstart.md) (Distribuire l'app catalogo di servizi tramite il portale di Azure).