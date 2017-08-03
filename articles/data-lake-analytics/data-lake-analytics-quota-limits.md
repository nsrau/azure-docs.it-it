---
title: Limiti di quota di Azure Data Lake Analytics | Documentazione Microsoft
description: Informazioni su come modificare e aumentare i limiti di quota nell'account di Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Azure Data Lake Analytics.
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limiti di quota di Azure Data Lake Analytics
Informazioni su come modificare e aumentare i limiti di quota nell'account di Azure Data Lake Analytics (ADLA). La conoscenza di questi limiti può aiutare a comprendere il comportamento del processo U-SQL. Tutti i limiti sono flessibili ed è sempre possibile rivolgersi a Microsoft per aumentare i limiti massimi.

## <a name="azure-subscriptions-limits"></a>Limiti delle sottoscrizioni Azure

**Numero massimo di account di ADLA per sottoscrizione:** 5. Questo è il numero massimo di account di Azure Data Lake Analytics che è possibile creare per ogni sottoscrizione. Se si tenta di creare un sesto account di ADLA, un messaggio di errore indica che è stato raggiunto il numero massimo di account di Data Lake Analytics consentiti (5) nell'area sotto il nome della sottoscrizione. È possibile risolvere facilmente questo problema, eliminando gli account di ADLA usati nella sottoscrizione o aprendo un ticket di supporto con Microsoft.

## <a name="adla-account-limits"></a>Limiti degli account di ADLA

**Numero massimo di unità di analisi per account:** 250. 

Questo è il numero massimo di unità di analisi che si possono eseguire contemporaneamente nell'account. Il numero totale di unità di analisi in tutti i processi non può superare questo valore. Se si supera il valore, i processi più recenti vengono inseriti automaticamente nella coda. ad esempio:

  * Se si ha un solo processo in esecuzione con 250 unità di analisi, quando si invia il secondo processo, quest'ultimo rimane nella coda finché il primo non viene completato.
  * Se sono già in esecuzione 5 processi e ognuno è stato inviato con 50 unità di analisi, quando si invia un sesto processo con, ad esempio, 20 unità di analisi, il processo rimane nella coda processi e inizia ad essere eseguito quando sono disponibili 20 unità di analisi.


**Numero massimo di processi simultanei di U-SQL per ogni account:** 20. 

Questo è il numero massimo di processi che si possono eseguire contemporaneamente nell'account. Se si supera il valore, i processi più recenti vengono inseriti automaticamente nella coda.

## <a name="adjust-the-adla-quota-limits-per-account"></a>Modificare i limiti di quota di Azure Data Lake Analytics per ogni account

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere l'account di Azure Data Lake Analytics già creato
3. Fare clic su **Proprietà**
4. Adeguare **Parallelismo** e **Processi simultanei** alle proprie esigenze.

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>Per aumentare i limiti di quota massimi

1. Aprire una richiesta di supporto nel portale di Azure.

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selezionare **Quota** come tipo di problema
3. Selezionare la propria **sottoscrizione** (assicurarsi che non sia una sottoscrizione di "prova").
4. Selezionare **Data Lake Analytics** come tipo di quota

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Nel pannello del problema, indicare la richiesta di aumenti dei limiti e i **dettagli** del motivo per cui viene richiesta la capacità aggiuntiva.

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verificare le informazioni di contatto e creare la richiesta di supporto.

Microsoft esamina la richiesta e tenta di eseguire l’operazione richiesta appena possibile.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

