---
title: "Risoluzione dei problemi: dati mancanti nei log attività scaricati di Azure Active Directory | Microsoft Docs"
description: "Offre una soluzione per i dati mancanti nei log attività scaricati di Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9fc5f8f3325f061e67fef053437963b167f4073b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Non è possibile trovare dati nei log attività di Azure Active Directory scaricati


## <a name="symptoms"></a>Sintomi

I log attività (controllo o accessi) sono stati scaricati ma non vengono visualizzati tutti i record per l'orario scelto. Perché? 

 ![Creazione di report](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Causa

Quando si scaricano i log attività nel portale di Azure, viene applicata una limitazione a 120.000 record, ordinati a partire dal più recente. 

## <a name="resolution"></a>Risoluzione

È possibile sfruttare le [API di Creazione rapporti di Azure AD](active-directory-reporting-api-getting-started.md) per recuperare fino a un milione di record per un momento specifico. L'approccio consigliato consiste nell'eseguire uno script in base a una pianificazione per chiamare le API di Creazione rapporti per recuperare i record in modo incrementale in un periodo di tempo specifico, ad esempio ogni giorno oppure ogni settimana.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Domande frequenti sulla creazione di report in Azure Active Directory](active-directory-reporting-faq.md).

