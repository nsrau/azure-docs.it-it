---
title: Limiti di quota di Azure Data Lake Analytics
description: Informazioni su come modificare e aumentare i limiti di quota nell'account di Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Azure Data Lake Analytics.
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 22774511720173915207da80a6ca33d5dbc83e19
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limiti di quota di Azure Data Lake Analytics

Informazioni su come modificare e aumentare i limiti di quota nell'account di Azure Data Lake Analytics (ADLA). La conoscenza di questi limiti può aiutare a comprendere il comportamento del processo U-SQL. Tutti i limiti di quota sono flessibili, quindi è possibile aumentare i limiti massimi contattando il supporto di Azure.

## <a name="azure-subscriptions-limits"></a>Limiti delle sottoscrizioni Azure

**Numero massimo di account di ADLA per sottoscrizione:**  5

Questo è il numero massimo di account di Azure Data Lake Analytics che è possibile creare per ogni sottoscrizione, per ogni area. Se si tenta di creare un sesto account di ADLA, si ottiene un messaggio di errore che indica che è stato raggiunto il numero massimo di account di Data Lake Analytics consentiti (5) nell'area sotto il nome della sottoscrizione. In questa situazione è possibile scegliere un'altra area, se appropriato, eliminare gli account ADLA inutilizzati nella stessa area o contattare il supporto di Azure [aprendo un ticket di supporto](#increase-maximum-quota-limits) per richiedere un aumento della quota.

## <a name="adla-account-limits"></a>Limiti degli account di ADLA

**Numero massimo di unità di analisi per account:** 250

Questo è il numero massimo di unità di analisi che si possono eseguire contemporaneamente nell'account. Se il numero totale di unità di analisi dei processi in esecuzione supera questo limite, i processi più recenti vengono accodati automaticamente. Ad esempio: 

* Se si ha un solo processo in esecuzione con 250 unità di analisi, quando si invia un secondo processo, esso rimarrà in attesa nella coda finché il primo non viene completato.
* Se si dispone di cinque processi in esecuzione e ognuno usa 50 Australia, quando si invia un processo sesto necessarie 20 AUs è in attesa nella coda dei processi fino a quando non sono presenti 20 AUs disponibili.

**Numero massimo di processi simultanei di U-SQL per ogni account:** 20

Questo è il numero massimo di processi che si possono eseguire contemporaneamente nell'account. Al di sopra di questo valore, i processi più recenti vengono accodati automaticamente.

## <a name="adjust-adla-quota-limits-per-account"></a>Modificare i limiti di quota di Azure Data Lake Analytics per ogni account

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere un account ADLA esistente.
3. Fare clic su **Proprietà**.
4. Adeguare **Parallelismo** e **Processi simultanei** alle proprie esigenze.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Aumentare i limiti massimi di quota

1. Aprire una richiesta di supporto nel portale di Azure.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selezionare il tipo di problema **Quota**.
3. Selezionare la propria **sottoscrizione** (assicurarsi che non sia una sottoscrizione di "prova").
4. Selezionare il tipo di quota **Data Lake Analytics**.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Nella pagina del problema indicare la richiesta di aumento del limite con i **dettagli** del motivo per cui viene richiesta la capacità aggiuntiva.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verificare le informazioni di contatto e creare la richiesta di supporto.

Microsoft esamina la richiesta e tenta di eseguire l’operazione richiesta appena possibile.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
