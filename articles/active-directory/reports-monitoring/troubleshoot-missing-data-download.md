---
title: 'Risoluzione dei problemi: dati mancanti nei log attività scaricati di Azure Active Directory | Microsoft Docs'
description: Offre una soluzione per i dati mancanti nei log attività scaricati di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3cf6296a5cf6bdf8697344595c0ead0908659c3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197467"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Non è possibile trovare tutti i dati nei log attività di Azure Active Directory scaricati

## <a name="symptoms"></a>Sintomi

I log attività (controllo o accessi) sono stati scaricati ma non vengono visualizzati tutti i record per l'orario scelto. Perché? 

 ![Creazione di report](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Quando si scaricano i log attività nel portale di Azure, viene applicata una limitazione a 5000 record, ordinati a partire dal più recente come primo. 

## <a name="resolution"></a>Risoluzione

È possibile sfruttare le [API di Creazione rapporti di Azure AD](concept-reporting-api.md) per recuperare fino a un milione di record per un momento specifico. L'approccio consigliato consiste nell'[eseguire uno script in base a una pianificazione](tutorial-signin-logs-download-script.md) per chiamare le API creazione report per recuperare i record in modo incrementale in un periodo di tempo specifico (ad esempio ogni giorno oppure ogni settimana). 

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sui report di Azure Active Directory](reports-faq.md)

